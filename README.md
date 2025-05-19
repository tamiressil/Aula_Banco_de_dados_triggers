import json
from .models import TbMcastCluster, TbMcastDemarcation, TbMcastNacional

def converter_para_dict(json_data):
    """ Garante que JSONs vindos como string sejam convertidos corretamente para dicionário. """
    if isinstance(json_data, str):
        return json.loads(json_data)  # Converte string JSON para dicionário
    return json_data  # Se já for dict, mantém

def filtrar_dict(json_data, chave_para_remover):
    """ Remove uma chave específica de um dicionário mantendo o restante. """
    if isinstance(json_data, dict) and chave_para_remover in json_data:
        resultado = json_data.copy()
        del resultado[chave_para_remover]
        return resultado
    return json_data

def get_city_data(cidade, uf):
    # Dados da tabela demarcation
    demarcation_data = TbMcastDemarcation.objects.filter(cidade=cidade, uf=uf).first()
    if not demarcation_data:
        return None
    
    # Processando dados de demarcation
    demarcation_dict = converter_para_dict(demarcation_data.json_body)
    filtrando_demarcation_dict = filtrar_dict(demarcation_dict, 'sigla')
    
    # Obtendo dados do cluster
    todas_as_regioes = TbMcastCluster.objects.all()
    cluster_data = None
    for regiao in todas_as_regioes:
        regioes_list = {r.strip().lower() for r in regiao.region.split("_")}
        uf_formatada = uf.strip().lower()
        if uf_formatada in regioes_list:
            cluster_data = regiao
            break
    
    # Processando dados de cluster
    cluster_dict = converter_para_dict(cluster_data.json_body if cluster_data else {})
    filtrando_cluster_dict = filtrar_dict(cluster_dict, 'region')
    
    # Obtendo dados nacionais
    tipo_nacional = "dsp" if uf == "SP" else "fsp"
    nacional_data = TbMcastNacional.objects.filter(dsp_fsp=tipo_nacional).first()
    nacional_dict = converter_para_dict(nacional_data.json_body if nacional_data else {})
    filtrando_nacional_dict = {key: value for key, value in nacional_dict.items() if key == "channels"}
    
    # Criando dicionário final seguindo a estrutura exata da imagem
    city_data = {
        "city": cidade,
        "cluster": filtrando_cluster_dict,
        "demarcation": filtrando_demarcation_dict,
        "federative_Unit": demarcation_data.uf.lower() if demarcation_data else None,
        "nacional": filtrando_nacional_dict,
        "nlc": demarcation_data.sigla.lower() if demarcation_data else None,
    }
    
    return city_data{% extends 'base.html' %}
{% load static %}
{% block title %}Detalhes da Cidade{% endblock %}
{% block content %}
{% for item in items %}
    <div class="json-container" style="font-family: monospace; padding: 20px; background-color: #000; color: #fff; border-radius: 5px;">
        <div style="margin-left: 20px;">"city": <span style="color: #adff2f;">"{{ item.city }}"</span>,</div>
        
        <div style="margin-left: 20px;">"cluster": [</div>
        {% for array in item.cluster.values %}
            <div style="margin-left: 40px;">[</div>
            {% for ip_address in array %}
                <div style="margin-left: 60px;"><span style="color: #adff2f;">"{{ ip_address }}"</span>{% if not forloop.last %},{% endif %}</div>
            {% endfor %}
            <div style="margin-left: 40px;">]{% if not forloop.last %},{% endif %}</div>
        {% endfor %}
        <div style="margin-left: 20px;">],</div>
        
        <div style="margin-left: 20px;">"demarcation": {</div>
        {% for key, value in item.demarcation.items %}
            <div style="margin-left: 40px;">"{{ key }}": 
            {% if value|stringformat:"s"|first == "{" %}
                {
                {% for subkey, subvalue in value.items %}
                    <div style="margin-left: 60px;">"{{ subkey }}": <span style="color: #adff2f;">"{{ subvalue }}"</span>{% if not forloop.last %},{% endif %}</div>
                {% endfor %}
                <div style="margin-left: 40px;">}</div>
            {% else %}
                <span style="color: #adff2f;">"{{ value }}"</span>
            {% endif %}
            {% if not forloop.last %},{% endif %}
            </div>
        {% endfor %}
        <div style="margin-left: 20px;">},</div>
        
        <div style="margin-left: 20px;">"federative_Unit": <span style="color: #adff2f;">"{{ item.federative_Unit }}"</span>,</div>
        
        <div style="margin-left: 20px;">"nacional": {</div>
        {% for key, value in item.nacional.items %}
            <div style="margin-left: 40px;">"{{ key }}": 
            {% if value|stringformat:"s"|first == "[" %}
                [
                {% for ip_address in value %}
                    <div style="margin-left: 60px;"><span style="color: #adff2f;">"{{ ip_address }}"</span>{% if not forloop.last %},{% endif %}</div>
                {% endfor %}
                <div style="margin-left: 40px;">]</div>
            {% else %}
                <span style="color: #adff2f;">"{{ value }}"</span>
            {% endif %}
            {% if not forloop.last %},{% endif %}
            </div>
        {% endfor %}
        <div style="margin-left: 20px;">},</div>
        
        <div style="margin-left: 20px;">"nlc": <span style="color: #adff2f;">"{{ item.nlc }}"</span></div>
    </div>
{% endfor %}
{% endblock %}
